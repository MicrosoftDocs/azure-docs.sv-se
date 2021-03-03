---
title: 'Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI med konfidentiella data behandlings noder'
description: Lär dig hur du skapar ett AKS-kluster med konfidentiella noder och distribuerar en Hello World-app med hjälp av Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/25/2020
ms.author: amgowda
ms.openlocfilehash: 51b0813849236d9335d1482019f740fc8b23749f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703294"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med konfidentiella beräknings noder (DCsv2) med hjälp av Azure CLI

Den här snabb starten är avsedd för utvecklare eller kluster operatörer som snabbt vill skapa ett AKS-kluster och distribuera ett program för att övervaka program med hjälp av den hanterade Kubernetes-tjänsten i Azure. Du kan också etablera klustret och lägga till hemliga data behandlings noder från Azure Portal.

## <a name="overview"></a>Översikt

I den här snabb starten får du lära dig hur du distribuerar ett Azure Kubernetes service-kluster (AKS) med konfidentiella databeräknings-noder med hjälp av Azure CLI och kör ett enkelt Hello World-program i en enklaven. AKS är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. Läs mer om AKS [här](../aks/intro-kubernetes.md).

> [!NOTE]
> Konfidentiell dator användning DCsv2 virtuella datorer utnyttjar specialiserad maskin vara som är föremål för högre priser och regions tillgänglighet. Mer information finns på sidan virtuella datorer för [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funktioner för att beräkna konfidentiella noder (DC <x> s-v2)

1. Linux Worker-noder som stöder Linux-behållare
1. Generation 2 VM med Ubuntu 18,04 Virtual Machines-noder
1. Intel SGX-baserad CPU med krypterad side cache-minne (EPC). Läs mer [här](./faq.md)
1. Stöd för Kubernetes-version 1.16 +
1. Intel SGX DCAP-drivrutinen förinstallerad på AKS-noderna. Läs mer [här](./faq.md)

## <a name="deployment-prerequisites"></a>Distributionskrav
I självstudien om distribution krävs följande:

1. En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar
1. Azure CLI version 2.0.64 eller senare installerat och konfigurerat på distributions datorn (kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
1. Minst sex **DC <x> s-v2-** kärnor som är tillgängliga i din prenumeration för användning. Som standard används kvoten för VM-kärnor för konfidentiella data behandling per Azure-prenumeration 8 kärnor. Om du planerar att etablera ett kluster som kräver mer än 8 kärnor, följer du [dessa](../azure-portal/supportability/per-vm-quota-requests.md) anvisningar för att öka en kvots öknings biljett

## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Skapa nytt AKS-kluster med konfidentiella databeräknings-noder och tillägg
Följ anvisningarna nedan för att lägga till funktioner för konfidentiell data behandling med tillägg.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Steg 1: skapa ett AKS-kluster med system Node-poolen

Om du redan har ett AKS-kluster som uppfyller ovanstående krav, [hoppar du till avsnittet befintligt kluster](#existing-cluster) för att lägga till en ny pool för konfidentiellt data behandling.

Skapa först en resurs grupp för klustret med kommandot AZ Group Create. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i *westus2* -regionen:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Skapa nu ett AKS-kluster med kommandot AZ AKS Create.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Ovanstående skapar ett nytt AKS-kluster med en adresspool med aktiverade tillägg. Fortsätt nu att lägga till en användar-nod för konfidentiell data behandling Nodepool-typ på AKS (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Steg 2: lägga till poolen för konfidentiella data behandling i AKS-kluster 

Kör kommandot nedan till en användare `Standard_DC2s_v2` med nodepool storlek med tre noder. Du kan välja andra lista över DCsv2 [SKU: er](../virtual-machines/dcv2-series.md)och regioner som stöds:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
Kommandot ovan är slutför en ny Node-pool med **DC <x> s-v2** ska vara synlig med konfidentiellt dator tillägg daemonsets ([SGX-enhetens plugin-program](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Steg 3: kontrol lera nodens pool och tillägg
Hämta autentiseringsuppgifterna för ditt AKS-kluster med kommandot AZ AKS get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Kontrol lera att noderna har skapats korrekt och att den SGX-relaterade daemonsets körs på **DC <x> s-v2 Node-** pooler med hjälp av kommandot kubectl get poddar & Nodes (se nedan):

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program.

Gå till [Hello World](#hello-world) distributions avsnittet för enklaven för att testa en app i en enklaven. Eller följ anvisningarna nedan om du vill lägga till fler resurspooler på AKS (AKS stöder mixning av SGX-nodkonfigurationer och noder i andra länder än SGX)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Lägga till poolen för konfidentiella data behandling i befintliga AKS-kluster<a id="existing-cluster"></a>

Det här avsnittet förutsätter att du har ett AKS-kluster som kör redan och som uppfyller de kriterier som anges i avsnittet krav (gäller tillägg).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Steg 1: Aktivera AKS-tillägget för konfidentiellt data behandling på det befintliga klustret

Kör kommandot nedan för att aktivera det konfidentiella dator tillägget

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Steg 2: Lägg till **DC <x> s-** User Node pool till klustret
    
> [!NOTE]
> För att kunna använda den konfidentiella data bearbetnings funktionen måste ditt befintliga AKS-kluster ha minst en **DC <x> s-v2 VM-** SKU baserad på VM. Läs mer om konfidentiell dator hantering DCsv2 VM SKU: [er här tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
kommandot ovan ska visa en lista över den senaste Node-poolen som du lade till med namnet confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Steg 3: kontrol lera att daemonsets körs på konfidentiella noder i pooler

Logga in på ditt befintliga AKS-kluster för att utföra verifieringen nedan. 

```console
kubectl get nodes
```
Utdata ska visa de nyligen tillagda confcompool1 i AKS-klustret.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program. Följ test program distributionen nedan.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World från isolerade enklaven-program <a id="hello-world"></a>
Skapa en fil med namnet *Hello-World-enklaven. yaml* och klistra in följande yaml-manifest. Du hittar den här öppna enklaven-baserade exempel program koden i det [Öppna enklaven-projektet](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Under distributionen nedan förutsätter vi att du har distribuerat addon-confcom.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Använd nu kommandot kubectl Apply för att skapa ett exempel jobb som ska startas i en säker enklaven, som du ser i följande exempel utdata:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Du kan bekräfta att arbets belastningen har skapat en enklaven (Trusted Execution Environment) genom att köra följande kommandon:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort associerade nodkonfigurationer eller ta bort AKS-klustret använder du följande kommandon:

Tar bort AKS-klustret
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Nästa steg

Köra python, Node osv. Program konfidentiellt genom konfidentiella behållare genom att besöka [exempel på konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples).

Kör enklaven-medvetna program genom att besöka [enklaven-medvetna Azure Container-exempel](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
