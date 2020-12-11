---
title: Använd GPU för beräknings acceleration eller VPU på Azure Stack gräns enheter för Kubernetes-distributioner | Microsoft Docs
description: Beskriver hur du använder GPU för beräknings acceleration eller VPU på din Azure Stack Edge Pro GPU, Azure Stack Edge Pro R eller Azure Stack Edge mini RI för Kubernetes-distributioner.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: cf70b24dae70ad2e64f3443e4c4d959d46fb4ea4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095040"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Använd beräknings acceleration på Azure Stack Edge Pro GPU för Kubernetes-distribution

Den här artikeln beskriver hur du använder beräknings acceleration på Azure Stack Edge-enheter när du använder Kubernetes-distributioner. Artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.


## <a name="about-compute-acceleration"></a>Om beräknings acceleration 

Den centrala bearbetnings enheten (CPU) är din standard beräkning för generell användning för de flesta processer som körs på en dator. En specialiserad dator maskin vara används ofta för att utföra vissa funktioner mer effektivt än att köra dem i program varan i en processor. En GPU (Graphics Processing Unit) kan till exempel användas för att påskynda bearbetningen av pixel data.  

Beräknings acceleration är en term som används specifikt för Azure Stack gräns enheter där en GPU (Graphic Processing Unit), en VPU (vision Processing Unit) eller en FPGA () används för maskin varu acceleration. De flesta arbets belastningar som distribueras på din Azure Stack Edge-enhet innefattar kritisk tid, flera kamera strömmar och/eller hög bild Rute hastigheter som kräver speciell maskin varu acceleration.

Artikeln diskuterar beräknings acceleration endast med GPU eller VPU för följande enheter:

- **Azure Stack Edge Pro GPU** – dessa enheter kan ha 1 eller 2 NVIDIA T4-processors KÄRN-GPU. Mer information finns i [NVIDIA-T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Pro R** – de här enheterna har en NVIDIA T4-plattform för processor kärnor. Mer information finns i [NVIDIA-T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).
- **Azure Stack Edge Mini R** – de här enheterna har 1 Intel Movidius MYRIADEN X VPU. Mer information finns i [Intel Movidius myriaden X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>Använd GPU för Kubernetes-distribution

Följande exempel `yaml` kan användas för en Azure Stack Edge Pro-GPU eller en Azure Stack Edge Pro R-enhet med en GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Använd VPU för Kubernetes-distribution

Följande exempel `yaml` kan användas för en Azure Stack Edge-Mini R-enhet som har en VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder kubectl för att köra ett Kubernetes-tillstånds känsligt program med en PersistentVolume på din Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
