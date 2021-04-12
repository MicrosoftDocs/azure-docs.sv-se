---
title: 'Snabb start: Distribuera ett AKS-kluster med konfidentiella databeräknings-noder med hjälp av Azure CLI'
description: Lär dig hur du skapar ett Azure Kubernetes service-kluster (AKS) med konfidentiella noder och distribuerar en Hello World-app med hjälp av Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283532"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Snabb start: Distribuera ett AKS-kluster med konfidentiella databeräknings-noder med hjälp av Azure CLI

I den här snabb starten använder du Azure CLI för att distribuera ett AKS-kluster (Azure Kubernetes service) med DCsv2-noder (konfidentiell data behandling). Sedan kör du ett enkelt Hello World-program i en enklaven. Du kan också etablera ett kluster och lägga till hemliga data behandlings noder från Azure Portal, men den här snabb starten fokuserar på Azure CLI.

AKS är en hanterad Kubernetes-tjänst som gör det möjligt för utvecklare eller kluster operatörer att snabbt distribuera och hantera kluster. Läs mer i [AKS-introduktionen](../aks/intro-kubernetes.md) och [ÖVERSIKTen över AKS-konfidentiella noder](confidential-nodes-aks-overview.md).

Funktioner i konfidentiella data behandlings noder är:

- Linux Worker-noder som stöder Linux-behållare.
- Virtuell dator i generation 2 med Ubuntu 18,04 VM-noder.
- Intel SGX-kompatibel processor för att hjälpa dig att köra dina behållare i konfidentialitets skydds enklaven utnyttjar krypterade Page cache-minnen (EPC). Mer information finns i [vanliga frågor och svar om Azures konfidentiella data behandling](./faq.md).
- Intel SGX DCAP-drivrutinen förinstallerad på de konfidentiella datorerna. Mer information finns i [vanliga frågor och svar om Azures konfidentiella data behandling](./faq.md).

> [!NOTE]
> Virtuella DCsv2-datorer använder specialiserad maskin vara som omfattas av högre priser och region tillgänglighet. Mer information finns i [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Azure CLI-version 2.0.64 eller senare installerat och konfigurerat på distributions datorn. 

  Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
- Minst sex DCsv2-kärnor som är tillgängliga i din prenumeration. 

  Som standard är kvoten för konfidentiella data behandling per Azure-prenumeration åtta VM-kärnor. Om du planerar att etablera ett kluster som kräver fler än åtta kärnor, följer du [dessa instruktioner](../azure-portal/supportability/per-vm-quota-requests.md) för att öka biljetten med kvot höjning.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Skapa ett AKS-kluster med konfidentiella databeräknings noder och tillägg

Använd följande instruktioner för att skapa ett AKS-kluster med det konfidentiella dator tillägget aktiverat, Lägg till en adresspool i klustret och kontrol lera vad du har skapat.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Skapa ett AKS-kluster med en pool för system-Node

> [!NOTE]
> Om du redan har ett AKS-kluster som uppfyller de krav som anges ovan, kan du [gå vidare till nästa avsnitt](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) för att lägga till en pool med konfidentiella data behandling.

Skapa först en resurs grupp för klustret med kommandot [AZ Group Create][az-group-create] . I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i regionen *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Skapa ett AKS-kluster med det konfidentiella dator tillägget aktiverat genom att använda kommandot [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Lägg till en adresspool för användare med konfidentiella data behandlings funktioner i AKS-klustret 

Kör följande kommando för att lägga till en adresspool för användare `Standard_DC2s_v2` med tre noder i AKS-klustret. Du kan välja en annan SKU från [listan över DCsv2 SKU: er och regioner som stöds](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

När du har kört kommandot bör en ny Node-pool med DCsv2 vara synlig med konfidentiellt data behandlings tillägg DaemonSets ([plugin-programmet SGX-enhet](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>Kontrol lera Node-poolen och-tillägget

Hämta autentiseringsuppgifterna för ditt AKS-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd `kubectl get pods` kommandot för att kontrol lera att noderna skapas korrekt och SGX-relaterade DaemonSets körs på DCsv2-noder:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om resultatet matchar föregående kod är ditt AKS-kluster nu redo att köra konfidentiella program.

Du kan gå till avsnittet [distribuera Hello World från ett isolerat enklaven program](#hello-world) i den här snabb starten för att testa en app i en enklaven. Eller Använd följande instruktioner för att lägga till fler noder i AKS. (AKS stöder mixning av SGX-nodkonfigurationer och andra noder än SGX.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Lägg till en pool för konfidentiell beräkning i ett befintligt AKS-kluster<a id="existing-cluster"></a>

I det här avsnittet förutsätter vi att du redan kör ett AKS-kluster som uppfyller de krav som anges ovan i den här snabb starten.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Aktivera AKS-tillägget för konfidentiellt data behandling på det befintliga klustret

Kör följande kommando för att aktivera det konfidentiella beräknings tillägget:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Lägga till en pool för DCsv2-användare i klustret

> [!NOTE]
> För att kunna använda den konfidentiella data behandlings funktionen måste ditt befintliga AKS-kluster ha minst en adresspool som baseras på en DCsv2 VM SKU. Om du vill veta mer om virtuella datorer i DCs-v2 för konfidentiell data behandling, se [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

Kör följande kommando för att skapa en Node-pool:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Kontrol lera att den nya Node-poolen med namnet *confcompool1* har skapats:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verifiera att DaemonSets körs på konfidentiella noder i pooler

Logga in på ditt befintliga AKS-kluster för att utföra följande verifiering:

```console
kubectl get nodes
```

Utdata ska visa den nyligen tillagda *confcompool1* -poolen i AKS-klustret. Du kan också se andra DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om resultatet matchar föregående kod är ditt AKS-kluster nu redo att köra konfidentiella program. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Distribuera Hello World från ett isolerat enklaven-program <a id="hello-world"></a>
Nu är du redo att distribuera ett testprogram. 

Skapa en fil med namnet *Hello-World-enklaven. yaml* och klistra in följande yaml-manifest. Du hittar den här exempel program koden i [Open enklaven-projektet](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Den här distributionen förutsätter att du har distribuerat *confcom* -tillägget.

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

Använd nu `kubectl apply` kommandot för att skapa ett exempel jobb som öppnas i en säker enklaven, vilket visas i följande exempel på utdata:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Du kan bekräfta att arbets belastningen har skapat en enklaven (Trusted Execution Environment) genom att köra följande kommandon:

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

Om du vill ta bort poolen för konfidentiella data behandling som du skapade i den här snabb starten använder du följande kommando: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Om du vill ta bort AKS-klustret använder du följande kommando: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Nästa steg

* Kör python, Node eller andra program via konfidentiella behållare med hjälp av de [konfidentiella container exemplen i GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Kör enklaven-medvetna program med hjälp av [enklaven-medvetna Azure Container-exempel i GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
