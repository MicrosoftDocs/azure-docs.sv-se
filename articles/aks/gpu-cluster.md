---
title: 'Använda GPU: er på Azure Kubernetes service (AKS)'
description: 'Lär dig hur du använder GPU: er för beräknings-och grafik intensiva arbets belastningar med höga prestanda i Azure Kubernetes service (AKS)'
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929148"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Använd GPU: er för beräknings intensiva arbets belastningar i Azure Kubernetes service (AKS)

Grafiska bearbetnings enheter (GPU: er) används ofta för beräknings intensiva arbets belastningar som grafik och visualiserings arbets belastningar. AKS stöder skapande av GPU-aktiverade nodkonfigurationer för att köra dessa beräknings intensiva arbets belastningar i Kubernetes. Mer information om tillgängliga GPU-aktiverade virtuella datorer finns i [GPU-optimerade VM-storlekar i Azure][gpu-skus]. För AKS-noder rekommenderar vi en minimal storlek på *Standard_NC6*.

> [!NOTE]
> GPU-aktiverade virtuella datorer innehåller specialiserad maskin vara som omfattas av högre priser och region tillgänglighet. Mer information finns i [pris][azure-pricing] verktyget och [regions tillgänglighet][azure-availability].

För närvarande är användningen av GPU-aktiverade noder bara tillgänglig för Linux-adresspooler.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster med noder som stöder GPU: er. Ditt AKS-kluster måste köra Kubernetes 1,10 eller senare. Om du behöver ett AKS-kluster som uppfyller dessa krav kan du läsa det första avsnittet i den här artikeln för att [skapa ett AKS-kluster](#create-an-aks-cluster).

Du måste också ha Azure CLI-versionen 2.0.64 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du behöver ett AKS-kluster som uppfyller minimi kraven (GPU-aktiverad nod och Kubernetes version 1,10 eller senare) utför du följande steg. Om du redan har ett AKS-kluster som uppfyller dessa krav kan du [gå vidare till nästa avsnitt](#confirm-that-gpus-are-schedulable).

Skapa först en resurs grupp för klustret med kommandot [AZ Group Create][az-group-create] . I följande exempel skapas ett resurs grupps namn *myResourceGroup* i regionen *östra* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu ett AKS-kluster med kommandot [AZ AKS Create][az-aks-create] . I följande exempel skapas ett kluster med en enda nod med storlek `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Hämta autentiseringsuppgifterna för ditt AKS-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Installera NVIDIA-enhets-plugin

Innan du kan använda GPU: er i noderna måste du distribuera en DaemonSet för NVIDIA-enhetens plugin-program. Den här DaemonSet kör en POD på varje nod för att tillhandahålla nödvändiga driv rutiner för GPU: er.

Börja med att skapa ett namn område med hjälp av kommandot [kubectl Create namespace][kubectl-create] , till exempel *GPU-resurser*:

```console
kubectl create namespace gpu-resources
```

Skapa en fil med namnet *NVIDIA-Device-plugin-DS. yaml* och klistra in följande yaml-manifest. Detta manifest tillhandahålls som en del av [NVIDIA-plugin-programmet för Kubernetes-projekt][nvidia-github].

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

Använd nu kommandot [kubectl Apply][kubectl-apply] för att skapa DaemonSet och bekräfta att NVIDIA-enhetens plugin-program har skapats, som visas i följande exempel på utdata:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Använd den specialiserade GPU-avbildningen för AKS (för hands version)

Som ett alternativ till dessa steg tillhandahåller AKS en helt konfigurerad AKS-avbildning som redan innehåller [NVIDIA-enhetens plugin-program för Kubernetes][nvidia-github].

> [!WARNING]
> Du bör inte installera NVIDIA Device plugin daemon manuellt för kluster med den nya AKS-specialiserade GPU-avbildningen.


Registrera `GPUDedicatedVHDPreview` funktionen:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](/cli/azure/feature#az-feature-list) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](/cli/azure/provider#az-provider-register) kommando:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

För att installera AKS-Preview CLI-tillägget använder du följande Azure CLI-kommandon:

```azurecli
az extension add --name aks-preview
```

För att uppdatera AKS-Preview CLI-tillägget använder du följande Azure CLI-kommandon:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Använd den specialiserade GPU-avbildningen AKS på nya kluster (förhands granskning)    

Konfigurera klustret så att det använder AKS-specialiserade GPU-avbildningen när klustret skapas. Använd `--aks-custom-headers` flaggan för GPU-agentens noder på det nya klustret för att använda den specialiserade GPU-avbildningen AKS.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Om du vill skapa ett kluster med hjälp av vanliga AKS-avbildningar kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen. Du kan också välja att lägga till fler specialiserade GPU Node-pooler enligt nedan.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Använd den specialiserade GPU-avbildningen AKS på befintliga kluster (för hands version)

Konfigurera en ny Node-pool så att den använder den specialiserade GPU-avbildningen AKS. Använd `--aks-custom-headers` flaggan flagga för GPU-agentens noder i den nya Node-poolen för att använda den specialiserade GPU-avbildningen AKS.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Om du vill skapa en resurspool med vanliga AKS-avbildningar kan du göra det genom att utesluta den anpassade `--aks-custom-headers` taggen. 

> [!NOTE]
> Om GPU-SKU: n kräver generation 2 virtuella datorer kan du skapa:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Bekräfta att GPU: er är schedulable

När ditt AKS-kluster har skapats kontrollerar du att GPU: er är schedulable i Kubernetes. Först listar du noderna i klustret med kommandot [kubectl get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Använd nu kommandot [kubectl beskriver Node][kubectl-describe] för att bekräfta att GPU: erna är schedulable. I avsnittet *kapacitet* ska GPU: en lista som `nvidia.com/gpu:  1` .

Följande komprimerade exempel visar att en GPU är tillgänglig på noden med namnet *AKS-nodepool1-18821093-0*:

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

## <a name="run-a-gpu-enabled-workload"></a>Köra en GPU-aktiverad arbets belastning

Om du vill se hur GPU fungerar schemalägger du en GPU-aktiverad arbets belastning med lämplig resurs förfrågan. I det här exemplet ska vi köra ett [Tensorflow](https://www.tensorflow.org/) -jobb mot [MNIST-datauppsättningen](http://yann.lecun.com/exdb/mnist/).

Skapa en fil med namnet *samples-TF-mnist-demo. yaml* och klistra in följande yaml-manifest. Följande jobb manifest innehåller en resurs gräns på `nvidia.com/gpu: 1` :

> [!NOTE]
> Om du får ett versions matchnings fel vid anrop till driv rutiner, till exempel om CUDA-drivrutinen inte är tillräcklig för CUDA runtime-version, går du igenom diagrammets diagram mat ris kompatibilitet för NVIDIA-drivrutinen- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

Använd kommandot [kubectl Apply][kubectl-apply] för att köra jobbet. Det här kommandot parsar manifest filen och skapar de definierade Kubernetes-objekten:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Visa status och utdata för GPU-aktiverade arbets belastningar

Övervaka förloppet för jobbet med kommandot [kubectl get Jobs][kubectl-get] med `--watch` argumentet. Det kan ta några minuter att först hämta avbildningen och bearbeta data uppsättningen. När kolumnen *slut för ande* visar *1/1* har jobbet slutförts. Avsluta `kubetctl --watch` kommandot med *CTRL-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Om du vill titta på utdata från GPU-aktiverade arbets belastningar hämtar du först namnet på Pod med kommandot [kubectl get poddar][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Använd nu kommandot [kubectl logs][kubectl-logs] för att Visa Pod-loggarna. I följande exempel Pod loggar bekräfta att rätt GPU-enhet har identifierats `Tesla K80` . Ange namnet på din egen pod:

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

Om du vill ta bort associerade Kubernetes-objekt som skapats i den här artikeln använder du kommandot [kubectl ta bort jobb][kubectl delete] på följande sätt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Nästa steg

Information om hur du kör Apache Spark jobb finns i [köra Apache Spark jobb på AKS][aks-spark].

Mer information om hur du kör arbets belastningar för Machine Learning (ML) på Kubernetes finns i [Kubeflow Labs][kubeflow-labs].

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
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
