---
title: Skapa en Windows Server-container i ett AKS-kluster med hjälp av Azure CLI
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuerar ett program i en Windows Server-container i Azure Kubernetes Service (AKS) med hjälp av Azure CLI.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 617590a3f482e246b8af5db6dd906591c16b20fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769435"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Skapa en Windows Server-container i ett Azure Kubernetes Service-kluster (AKS) med hjälp av Azure CLI

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här artikeln distribuerar du ett AKS-kluster med hjälp av Azure CLI. Du distribuerar även ASP.NET ett exempelprogram i en Windows Server-container till klustret.

![Bild som visar hur du bläddrar ASP.NET ett exempelprogram](media/windows-container/asp-net-sample-app.png)

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera nodpooler:

* Du kan inte ta bort den första nodpoolen.

Följande ytterligare begränsningar gäller för Windows Server-nodpooler:

* AKS-klustret kan ha högst 10 nodpooler.
* AKS-klustret kan ha högst 100 noder i varje nodpool.
* Namnet på Windows Server-nodpoolen har en gräns på 6 tecken.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är där metadata för resursgrupper lagras. Det är också där dina resurser körs i Azure om du inte anger en annan region när resursen skapas. Skapa en resursgrupp med hjälp av kommandot [az group create][az-group-create].

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

> [!NOTE]
> I den här artikeln används Bash-syntax för kommandona i den här självstudien.
> Om du använder Azure Cloud Shell ser du till att listrutan längst upp till vänster i Cloud Shell-fönstret är inställd på **Bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Följande exempelutdata visar den resursgrupp som skapats:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du vill köra ett AKS-kluster som stöder nodpooler för Windows Server-containrar måste klustret använda en nätverksprincip som [använder Azure CNI][azure-cni-about] (avancerat) plugin-program för nätverk. Mer detaljerad information som hjälper dig att planera de nödvändiga undernätsintervallen och nätverksöverväganden finns i [konfigurera Azure CNI nätverk][use-advanced-networking]. Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster med namnet *myAKSCluster*. Det här kommandot skapar nödvändiga nätverksresurser om de inte finns.

* Klustret konfigureras med två noder.
* Parametrarna `--windows-admin-password` `--windows-admin-username` och anger administratörsautentiseringsuppgifterna för alla Windows Server-containrar som skapas i klustret och måste uppfylla [lösenordskraven för Windows Server.][windows-server-password] Om du inte anger parametern *windows-admin-password* uppmanas du att ange ett värde.
* Nodpoolen använder `VirtualMachineScaleSets` .

> [!NOTE]
> För att säkerställa att klustret fungerar tillförlitligt bör du köra minst 2 (två) noder i standardnodpoolen.

Skapa ett användarnamn som ska användas som administratörsautentiseringsuppgifter för dina Windows Server-containrar i klustret. Följande kommandon uppmanar dig att ange ett användarnamn och ange WINDOWS_USERNAME för användning i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett BASH-gränssnitt).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Skapa klustret och se till att du anger `--windows-admin-username` parametern. Följande exempelkommando skapar ett kluster med hjälp av värdet från *WINDOWS_USERNAME* som du konfigurerade i föregående kommando. Du kan också ange ett annat användarnamn direkt i parametern i stället för att använda *WINDOWS_USERNAME*. Följande kommando uppmanar dig också att skapa ett lösenord för administratörsautentiseringsuppgifterna för dina Windows Server-containrar i klustret. Du kan också använda parametern *windows-admin-password* och ange ditt eget värde där.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Om du får ett lösenordsverifieringsfel kontrollerar du att det lösenord som du har angett uppfyller [Windows Server-lösenordskraven.][windows-server-password] Om lösenordet uppfyller kraven kan du försöka skapa resursgruppen i en annan region. Prova sedan att skapa klustret med den nya resursgruppen.

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Ibland kan det ta längre tid än några minuter att etablera klustret. Tillåt i dessa fall upp till 10 minuter.

## <a name="add-a-windows-server-node-pool"></a>Lägga till en Windows Server-nodpool

Som standard skapas ett AKS-kluster med en nodpool som kan köra Linux-containrar. Använd `az aks nodepool add` kommandot för att lägga till ytterligare en nodpool som kan köra Windows Server-containrar tillsammans med Linux-nodpoolen.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Kommandot ovan skapar en ny nodpool med namnet *npwin* och lägger till den i *myAKSCluster*. När du skapar en nodpool för att köra Windows Server-containrar är *standardvärdet för node-vm-size* *Standard_D2s_v3*. Om du väljer att ange *parametern node-vm-size* kontrollerar du listan över [begränsade VM-storlekar.][restricted-vm-sizes] Den minsta rekommenderade storleken är *Standard_D2s_v3*. Kommandot ovan använder också standardundernätet i det virtuella standardnätverk som skapas när du kör `az aks create` .

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```console
kubectl get nodes
```

Följande exempelutdata visar alla noder i klustret. Kontrollera att statusen för alla noder är *Klar:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Kör programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här artikeln används ett manifest för att skapa alla objekt som behövs för att köra ASP.NET exempelprogrammet i en Windows Server-container. Det här manifestet [innehåller en Kubernetes-distribution][kubernetes-deployment] för ASP.NET-exempelprogrammet och en extern [Kubernetes-tjänst][kubernetes-service] för att komma åt programmet från Internet.

Den ASP.NET exempelprogrammet tillhandahålls som en del av [.NET Framework Samples och][dotnet-samples] körs i en Windows Server-container. AKS kräver att Windows Server-containrar baseras på avbildningar *av Windows Server 2019* eller högre. Kubernetes-manifestfilen måste också [][node-selector] definiera en nodväljare som anger att ditt AKS-kluster ska köra ASP.NET-exempelprogrammets podd på en nod som kan köra Windows Server-containrar.

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

```console
kubectl apply -f sample.yaml
```

Följande exempelutdata visar att distributionen och tjänsten har skapats:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra. Ibland kan det ta längre tid än några minuter att etablera tjänsten. I dessa fall kan det ta upp till 10 minuter.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```console
kubectl get service sample --watch
```

Till en *början visas EXTERNAL-IP* *för* exempeltjänsten *som väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se exempelappen i praktiken öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som visar hur du bläddrar ASP.NET ett exempelprogram](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Om du får en tidsgräns för anslutning när du försöker läsa in sidan bör du kontrollera att exempelappen är klar med följande kommando [kubectl get pods --watch]. Ibland startas inte Windows-containern när den externa IP-adressen är tillgänglig.

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs kan du använda kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

I den här artikeln distribuerade du ett Kubernetes-kluster och distribuerade ASP.NET ett exempelprogram i en Windows Server-container till det. [Öppna Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för det kluster som du nyss skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference