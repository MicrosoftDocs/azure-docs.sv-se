---
title: Använda GPU:er på Azure Kubernetes Service (AKS)
description: Lär dig hur du använder GPU:er för databehandling med höga prestanda eller grafikintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5e36465c307443c8e6f135c5937bddbbb079b60e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783169"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Använda GPU:er för beräkningsintensiva arbetsbelastningar på Azure Kubernetes Service (AKS)

Grafiska bearbetningsenheter (GPU: er) används ofta för beräkningsintensiva arbetsbelastningar, till exempel grafik- och visualiseringsarbetsbelastningar. AKS stöder skapandet av GPU-aktiverade nodpooler för att köra dessa beräkningsintensiva arbetsbelastningar i Kubernetes. Mer information om tillgängliga GPU-aktiverade virtuella datorer finns i [GPU-optimerade VM-storlekar i Azure.][gpu-skus] För AKS-noder rekommenderar vi en minsta storlek på *Standard_NC6*.

> [!NOTE]
> GPU-aktiverade virtuella datorer innehåller specialiserad maskinvara som omfattas av högre priser och regionstillgänglighet. Mer information finns i prisverktyget [och][azure-pricing] [regionstillgänglighet.][azure-availability]

För närvarande är gpu-aktiverade nodpooler endast tillgängliga för Linux-nodpooler.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med noder som stöder GPU:er. AKS-klustret måste köra Kubernetes 1.10 eller senare. Om du behöver ett AKS-kluster som uppfyller dessa krav kan du läsa det första avsnittet i den här artikeln för [att skapa ett AKS-kluster.](#create-an-aks-cluster)

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du behöver ett AKS-kluster som uppfyller minimikraven (GPU-aktiverad nod och Kubernetes version 1.10 eller senare) utför du följande steg. Om du redan har ett AKS-kluster som uppfyller dessa krav [går du vidare till nästa avsnitt.](#confirm-that-gpus-are-schedulable)

Skapa först en resursgrupp för klustret med kommandot [az group create.][az-group-create] I följande exempel skapas resursgruppen *myResourceGroup* i *regionen eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu ett AKS-kluster med [kommandot az aks create.][az-aks-create] I följande exempel skapas ett kluster med en enda nod med storleken `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Hämta autentiseringsuppgifterna för ditt [AKS-kluster med kommandot az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Installera PLUGIN-programmet NVIDIA-enhet

Innan GPU:erna i noderna kan användas måste du distribuera en DaemonSet för PLUGIN-programmet NVIDIA-enhet. Denna DaemonSet kör en podd på varje nod för att tillhandahålla nödvändiga drivrutiner för GPU:erna.

Skapa först ett namnområde med kommandot [kubectl create namespace,][kubectl-create] till exempel *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Skapa en fil med *namnet nvidia-device-plugin-ds.yaml* och klistra in följande YAML-manifest. Det här manifestet tillhandahålls som en del av [NVIDIA-enhets plugin-programmet för Kubernetes-projektet][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Använd nu kommandot [kubectl apply][kubectl-apply] för att skapa DaemonSet och bekräfta att nvidia-enhetens plugin-program har skapats, som du ser i följande exempelutdata:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Använda den AKS-specialiserade GPU-avbildningen (förhandsversion)

Som ett alternativ till de här stegen tillhandahåller AKS en fullständigt konfigurerad AKS-avbildning som redan innehåller [PLUGIN-programmet NVIDIA-enhet för Kubernetes.][nvidia-github]

> [!WARNING]
> Du bör inte installera NVIDIA-enhets-plugin-daemonuppsättningen manuellt för kluster som använder den nya AKS-specialiserade GPU-avbildningen.


Registrera `GPUDedicatedVHDPreview` funktionen:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter för statusen att visas som **Registrerad.** Du kan kontrollera registreringsstatusen med hjälp av [kommandot az feature list:](/cli/azure/feature#az_feature_list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resursprovidern med hjälp av [kommandot az provider register:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Använd följande Azure CLI-kommandon för att installera CLI-tillägget aks-preview:

```azurecli
az extension add --name aks-preview
```

Om du vill uppdatera CLI-tillägget aks-preview använder du följande Azure CLI-kommandon:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Använda den AKS-specialiserade GPU-avbildningen i nya kluster (förhandsversion)    

Konfigurera klustret att använda den AKS-specialiserade GPU-avbildningen när klustret skapas. Använd flaggan `--aks-custom-headers` för GPU-agentnoderna i det nya klustret för att använda den AKS-specialiserade GPU-avbildningen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Om du vill skapa ett kluster med vanliga AKS-avbildningar kan du göra det genom att utelämna den anpassade `--aks-custom-headers` taggen. Du kan också välja att lägga till mer specialiserade GPU-nodpooler enligt nedan.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Använda den AKS-specialiserade GPU-avbildningen i befintliga kluster (förhandsversion)

Konfigurera en ny nodpool för att använda den AKS-specialiserade GPU-avbildningen. Använd flaggan `--aks-custom-headers` för GPU-agentnoderna i den nya nodpoolen för att använda den AKS-specialiserade GPU-avbildningen.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Om du vill skapa en nodpool med vanliga AKS-avbildningar kan du göra det genom att utelämna den anpassade `--aks-custom-headers` taggen. 

> [!NOTE]
> Om GPU-SKU:n kräver virtuella datorer i generation 2 kan du skapa följande:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Bekräfta att GPU:er kan användas

När AKS-klustret har skapats bekräftar du att GPU:er kan användas i Kubernetes. Börja med att lista noderna i klustret med kommandot [kubectl get nodes:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Använd nu [kommandot kubectl describe node][kubectl-describe] för att bekräfta att GPU:erna kan användas. Under avsnittet *Kapacitet* ska GPU:n listas som `nvidia.com/gpu:  1` .

Följande komprimerade exempel visar att en GPU är tillgänglig på noden med namnet *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Köra en GPU-aktiverad arbetsbelastning

Om du vill se GPU i praktiken schemalägger du en GPU-aktiverad arbetsbelastning med lämplig resursbegäran. I det här exemplet ska vi köra ett [Tensorflow-jobb](https://www.tensorflow.org/) mot [MNIST-datauppsättning](http://yann.lecun.com/exdb/mnist/).

Skapa en fil med *namnet samples-tf-mnist-demo.yaml* och klistra in följande YAML-manifest. Följande jobbmanifest innehåller en resursgräns på `nvidia.com/gpu: 1` :

> [!NOTE]
> Om du får ett felmatchat versionsfel när du anropar drivrutiner, till exempel om CUDA-drivrutinsversionen inte är tillräcklig för CUDA-körningsversionen, granskar du kompatibilitetsdiagrammet för NVIDIA-drivrutinsmatrisen – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Använd kommandot [kubectl apply][kubectl-apply] för att köra jobbet. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Visa status och utdata för den GPU-aktiverade arbetsbelastningen

Övervaka jobbets förlopp med kommandot [kubectl get jobs][kubectl-get] med `--watch` argumentet . Det kan ta några minuter att först hämta avbildningen och bearbeta datauppsättningen. När *kolumnen COMPLETIONS* visar *1/1* har jobbet slutförts. Avsluta kommandot `kubetctl --watch` med *Ctrl-C:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Om du vill titta på utdata från den GPU-aktiverade arbetsbelastningen hämtar du först namnet på podden med [kommandot kubectl get pods:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Använd nu kommandot [kubectl logs][kubectl-logs] för att visa poddloggarna. Följande exempel på poddloggar bekräftar att rätt GPU-enhet har identifierats, `Tesla K80` . Ange namnet på din egen podd:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort associerade Kubernetes-objekt som skapats i den här artikeln använder du [kommandot kubectl delete job][kubectl delete] enligt följande:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Nästa steg

Om du Apache Spark jobb kan du gå [till Apache Spark jobb på AKS.][aks-spark]

Mer information om hur du kör maskininlärningsarbetsbelastningar (ML) på Kubernetes finns [i Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
