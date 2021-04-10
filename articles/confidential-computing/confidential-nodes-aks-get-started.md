---
title: 'Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI med konfidentiella data behandlings noder'
description: I den här snabb starten får du lära dig hur du skapar ett AKS-kluster med konfidentiella noder och distribuerar en Hello World-app med hjälp av Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933142"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med konfidentiella beräknings noder (DCsv2) med hjälp av Azure CLI

Den här snabb starten är avsedd för utvecklare eller kluster operatörer som snabbt vill skapa ett AKS-kluster och distribuera ett program för att övervaka program med hjälp av den hanterade Kubernetes-tjänsten i Azure. Du kan också etablera klustret och lägga till hemliga data behandlings noder från Azure Portal.

## <a name="overview"></a>Översikt

I den här snabb starten får du lära dig hur du distribuerar ett Azure Kubernetes service-kluster (AKS) med konfidentiella databeräknings-noder med hjälp av Azure CLI och kör ett enkelt Hello World-program i en enklaven. AKS är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. Läs mer i [AKS-introduktionen](../aks/intro-kubernetes.md) och [Översikt över AKS-konfidentiella noder](confidential-nodes-aks-overview.md).

> [!NOTE]
> Konfidentiell dator användning DCsv2 virtuella datorer utnyttjar specialiserad maskin vara som är föremål för högre priser och regions tillgänglighet. Mer information finns på sidan virtuella datorer för [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Funktioner för att beräkna konfidentiella noder (DCsv2)

1. Linux Worker-noder som stöder Linux-behållare.
1. Generation 2 virtuell dator med Ubuntu 18,04 Virtual Machines-noder.
1. Intel SGX-baserad CPU med krypterad side cache-minne (EPC). Läs mer [här](./faq.md).
1. Stöd för Kubernetes-version 1.16 +.
1. Intel SGX DCAP-drivrutinen förinstallerad på AKS-noderna. Läs mer [här](./faq.md).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

1. En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
1. Azure CLI version 2.0.64 eller senare installerat och konfigurerat på distributions datorn (kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Minst sex **DCsv2** -kärnor som är tillgängliga i din prenumeration för användning. Som standard är kvoten för VM-kärnor för konfidentiella data behandling per Azure-prenumeration åtta kärnor. Om du planerar att etablera ett kluster som kräver fler än åtta kärnor, följer du [dessa](../azure-portal/supportability/per-vm-quota-requests.md) instruktioner för att öka kvoten för att öka kvoten.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Skapa ett nytt AKS-kluster med konfidentiella databeräknings noder och tillägg

Följ anvisningarna nedan för att lägga till funktioner för konfidentiell data behandling med tillägg.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Skapa ett AKS-kluster med en pool för system-Node

Om du redan har ett AKS-kluster som uppfyller ovanstående krav, [hoppar du till avsnittet befintligt kluster](#existing-cluster) för att lägga till en ny pool för konfidentiellt data behandling.

Skapa först en resurs grupp för klustret med kommandot [AZ Group Create][az-group-create] . I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i *westus2* -regionen:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Skapa nu ett AKS-kluster med kommandot [AZ AKS Create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

Ovanstående skapar ett nytt AKS-kluster med en adresspool med aktiverade tillägg. Lägg sedan till en adresspool för användare med konfidentiella data behandlings funktioner i AKS-klustret.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Lägg till en pool för konfidentiell beräkning i AKS-klustret 

Kör följande kommando för att lägga till en adresspool för användare `Standard_DC2s_v2` med tre noder. Du kan välja en annan SKU från listan över [DCsv2 SKU: er och regioner](../virtual-machines/dcv2-series.md)som stöds.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Efter körningen bör en ny Node-pool med **DCsv2** vara synlig med konfidentiella dator tillägg Daemonsets ([SGX-enhetens plugin-program](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Kontrol lera Node-poolen och-tillägget

Hämta autentiseringsuppgifterna för ditt AKS-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kontrol lera att noderna har skapats korrekt och att den SGX-relaterade daemonsets körs på **DCsv2** Node-pooler med kubectl hämta poddar & Nodes (se nedan):

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program.

Gå till avsnittet [Hello World från enklaven](#hello-world) -distribution om du vill testa en app i en enklaven. Du kan också följa anvisningarna nedan om du vill lägga till fler resurspooler på AKS (AKS stöder mixning av SGX-nodkonfigurationer och noder som inte är SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Lägg till en pool för konfidentiell beräkning i ett befintligt AKS-kluster<a id="existing-cluster"></a>

Det här avsnittet förutsätter att du har ett AKS-kluster som kör redan och som uppfyller de kriterier som anges i avsnittet krav (gäller tillägg).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Aktivera AKS-tillägget för konfidentiellt data behandling på det befintliga klustret

Kör följande kommando för att aktivera det konfidentiella beräknings tillägget:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Lägga till en pool för **DCsv2** -användare i klustret

> [!NOTE]
> För att kunna använda den konfidentiella data behandlings funktionen måste ditt befintliga AKS-kluster ha minst en **DCsv2** VM-baserad Node-pool. Mer information om konfidentiell data behandling DCs-v2 VM SKU: er finns i [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

Kör följande kommando för att skapa en ny Node-pool:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Kontrol lera att den nya Node-poolen med namnet confcompool1 har skapats:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verifiera att daemonsets körs på konfidentiella noder i pooler

Logga in på ditt befintliga AKS-kluster för att utföra följande verifiering.

```console
kubectl get nodes
```

Utdata ska visa de nyligen tillagda confcompool1 i AKS-klustret. Du kan också se andra daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program. Följ anvisningarna nedan om du vill distribuera ett testprogram.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World från isolerade enklaven-program <a id="hello-world"></a>
Skapa en fil med namnet *Hello-World-enklaven. yaml* och klistra in följande yaml-manifest. Du hittar den här öppna enklaven-baserade exempel program koden i det [Öppna enklaven-projektet](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Följande distribution förutsätter att du har distribuerat addon-confcom.

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

Om du vill ta bort associerade nodkonfigurationer eller ta bort AKS-klustret använder du följande kommandon:

### <a name="remove-the-confidential-computing-node-pool"></a>Ta bort poolen för konfidentiella data behandling

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Ta bort AKS-klustret

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Nästa steg

* Kör python, Node osv. program konfidentiellt genom konfidentiella behållare genom att besöka [exempel på konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples).

* Kör enklaven-medvetna program genom att besöka [enklaven-medvetna Azure Container-exempel](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials