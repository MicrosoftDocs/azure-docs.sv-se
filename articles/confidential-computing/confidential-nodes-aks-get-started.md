---
title: 'Snabbstart: Distribuera ett AKS-kluster med noder för konfidentiell databehandling med hjälp av Azure CLI'
description: Lär dig hur du skapar Azure Kubernetes Service kluster (AKS) med konfidentiella noder och distribuerar en Hello World-app med hjälp av Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 261deb0c4f5f28be51e806ab76261278709efc3b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482882"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Snabbstart: Distribuera ett AKS-kluster med noder för konfidentiell databehandling med hjälp av Azure CLI

I den här snabbstarten använder du Azure CLI för att distribuera ett Azure Kubernetes Service-kluster (AKS) med noder för konfidentiell databehandling (DCsv2). Sedan kör du ett enkelt Hello World program i en enklav. Du kan också etablera ett kluster och lägga till noder för konfidentiell databehandling från Azure Portal, men den här snabbstarten fokuserar på Azure CLI.

AKS är en hanterad Kubernetes-tjänst som gör det möjligt för utvecklare eller klusteroperatörer att snabbt distribuera och hantera kluster. Mer information finns i [introduktionen till AKS och](../aks/intro-kubernetes.md) översikten [över konfidentiella AKS-noder.](confidential-nodes-aks-overview.md)

Funktionerna i noder för konfidentiell databehandling är:

- Linux-arbetsnoder som stöder Linux-containrar.
- Virtuell generation 2-dator (VM) med Ubuntu 18.04 VM-noder.
- Intel SGX-kompatibel PROCESSOR som hjälper dig att köra dina containrar i sekretessskyddad enklav med hjälp av EPC (Encrypted Page Cache Memory). Mer information finns i [Vanliga frågor och svar om konfidentiell databehandling i Azure.](./faq.md)
- Intel SGX DCAP Driver förinstalleras på de konfidentiella beräkningsnoderna. Mer information finns i [Vanliga frågor och svar om konfidentiell databehandling i Azure.](./faq.md)

> [!NOTE]
> Virtuella DCsv2-datorer använder specialiserad maskinvara som omfattas av högre priser och regionstillgänglighet. Mer information finns i tillgängliga [SKU:er och regioner som stöds.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI version 2.0.64 eller senare installeras och konfigureras på distributionsdatorn. 

  Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
- Minst sex DCsv2-kärnor är tillgängliga i din prenumeration. 

  Som standard är kvoten för konfidentiell databehandling per Azure-prenumeration åtta kärnor för virtuella datorer. Om du planerar att etablera ett kluster som kräver fler än åtta kärnor följer du dessa instruktioner [för](../azure-portal/supportability/per-vm-quota-requests.md) att skapa en kvotökningsbiljett.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Skapa ett AKS-kluster med konfidentiella beräkningsnoder och tillägg

Använd följande instruktioner för att skapa ett AKS-kluster med tillägget för konfidentiell databehandling aktiverat, lägg till en nodpool i klustret och kontrollera vad du har skapat.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Skapa ett AKS-kluster med en systemnodpool

> [!NOTE]
> Om du redan har ett AKS-kluster som uppfyller de krav som anges tidigare [går](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) du vidare till nästa avsnitt för att lägga till en konfidentiell nodpool för databehandling.

Skapa först en resursgrupp för klustret med hjälp av [kommandot az group][az-group-create] create. I följande exempel skapas en resursgrupp med *namnet myResourceGroup* i *regionen westus2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Skapa nu ett AKS-kluster med tillägget för konfidentiell databehandling aktiverat med hjälp av [kommandot az aks create:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Lägga till en användarnodpool med funktioner för konfidentiell databehandling i AKS-klustret 

Kör följande kommando för att lägga till en användarnodpool `Standard_DC2s_v2` med storleken tre noder i AKS-klustret. Du kan välja en annan SKU i listan [över DCsv2-SKU:er och regioner som stöds.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

När du har kört kommandot ska en ny nodpool med DCsv2 vara synlig med daemonSets för konfidentiell databehandling[(SGX-enhets-plugin-programmet).](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)

### <a name="verify-the-node-pool-and-add-on"></a>Verifiera nodpoolen och tillägget

Hämta autentiseringsuppgifterna för ditt AKS-kluster med hjälp av [kommandot az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd kommandot för att kontrollera att noderna har skapats korrekt och `kubectl get pods` att SGX-relaterade DaemonSets körs på DCsv2-nodpooler:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om utdata matchar föregående kod är ditt AKS-kluster nu redo att köra konfidentiella program.

Du kan gå till [avsnittet Distribuera Hello World](#hello-world) från ett isolerat enklavprogram i den här snabbstarten för att testa en app i en enklav. Eller använd följande instruktioner för att lägga till fler nodpooler i AKS. (AKS stöder blandning av SGX-nodpooler och icke-SGX-nodpooler.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Lägga till en nodpool för konfidentiell databehandling i ett befintligt AKS-kluster<a id="existing-cluster"></a>

Det här avsnittet förutsätter att du redan kör ett AKS-kluster som uppfyller de krav som anges tidigare i den här snabbstarten.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Aktivera AKS-tillägget för konfidentiell databehandling i det befintliga klustret

Kör följande kommando för att aktivera tillägget för konfidentiell databehandling:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Lägga till en DCsv2-användarnodpool i klustret

> [!NOTE]
> Om du vill använda funktionen för konfidentiell databehandling måste ditt befintliga AKS-kluster ha minst en nodpool som baseras på en DCsv2 VM SKU. Mer information om SKU:er för virtuella DCs-v2-datorer för konfidentiell databehandling finns i tillgängliga [SKU:er och regioner som stöds.](virtual-machine-solutions.md)

Kör följande kommando för att skapa en nodpool:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Kontrollera att den nya nodpoolen med *namnet confcompool1* har skapats:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Kontrollera att DaemonSets körs på konfidentiella nodpooler

Logga in på ditt befintliga AKS-kluster för att utföra följande verifiering:

```console
kubectl get nodes
```

Utdata bör visa den nyligen tillagda *confcompool1-poolen* i AKS-klustret. Du kan också se andra DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om utdata matchar föregående kod är ditt AKS-kluster nu redo att köra konfidentiella program. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Distribuera Hello World från ett isolerat enklavprogram <a id="hello-world"></a>
Nu är du redo att distribuera ett testprogram. 

Skapa en fil med *namnet hello-world-enclave.yaml* och klistra in följande YAML-manifest. Du hittar den här exempelprogramkoden i [Open Enclave-projektet](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Den här distributionen förutsätter att du har distribuerat *confcom-tillägget.*

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
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Använd nu kommandot för att skapa ett exempeljobb som öppnas i en säker `kubectl apply` enklav, som du ser i följande exempelutdata:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Du kan bekräfta att arbetsbelastningen har skapat en miljö för betrodd körning (enklav) genom att köra följande kommandon:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort nodpoolen för konfidentiell databehandling som du skapade i den här snabbstarten använder du följande kommando: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Om du vill ta bort AKS-klustret använder du följande kommando: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Nästa steg

* Kör Python, Node eller andra program via konfidentiella containrar med hjälp av [konfidentiella containerexempel i GitHub.](https://github.com/Azure-Samples/confidential-container-samples)

* Kör enklavmedvetna program med hjälp av [enklavmedvetna Azure-containerexempel i GitHub.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
