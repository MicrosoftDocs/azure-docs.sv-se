---
title: GPU-delning på Azure Stack Edge Pro GPU-enhet
description: 'Beskriver metoder för att dela GPU: er på Azure Stack Edge Pro GPU-enhet.'
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565277"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>GPU-delning på din Azure Stack Edge Pro GPU-enhet

GPU (Graphics Processing Unit) är en specialiserad processor som är utformad för att påskynda bild åter givning. GPU: er kan bearbeta många delar av data samtidigt, vilket gör dem användbara för maskin inlärning, video redigering och spel program. Förutom CPU för beräkning av generell användning kan dina Azure Stack Edge Pro GPU-enheter innehålla en eller två NVIDIA Tesla T4-GPU: er för beräknings intensiva arbets belastningar som maskin varu accelererad inferencing. Mer information finns i [NVIDIA: s Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>Om GPU-delning

Många maskin inlärninger eller andra beräknings arbets belastningar kanske inte behöver en dedikerad GPU. GPU: er kan delas och delas av GPU: er mellan behållare eller VM-arbetsbelastningar hjälper till att öka GPU-användningen utan att avsevärt påverka prestanda fördelarna med GPU.  

## <a name="using-gpu-with-vms"></a>Använda GPU med virtuella datorer

På din Azure Stack Edge Pro-enhet kan en GPU inte delas när du distribuerar VM-arbetsbelastningar. En GPU kan bara mappas till en virtuell dator. Detta innebär att du bara kan ha en GPU-VM på en enhet med en GPU och två virtuella datorer på en enhet som är utrustad med två GPU: er. Det finns andra faktorer som även måste beaktas när du använder virtuella GPU-datorer på en enhet som har Kubernetes som kon figurer ATS för arbets belastningar i behållare. Mer information finns i GPU-VM: ar [och Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Använda GPU med behållare

Om du distribuerar behållar arbets belastningar kan en GPU delas på fler än ett sätt på maskin-och program varu nivå. Med Tesla T4-GPU: n på din Azure Stack Edge Pro-enhet är vi begränsad till program varu delning. På din enhet används följande två metoder för program varu delning av GPU: 

- Den första metoden innebär att använda miljövariabler för att ange hur många GPU-enheter som kan delas. Ta hänsyn till följande varningar när du använder den här metoden:

    - Du kan ange en eller båda eller inga GPU: er med den här metoden. Det går inte att ange bråk användning.
    - Flera moduler kan mappas till en GPU, men samma modul kan inte mappas till fler än en GPU.
    - Med NVIDIA SMI-utdata kan du se den övergripande GPU-användningen, inklusive minnes användningen.
    
    Mer information finns i [distribuera en IoT Edge-modul som använder GPU](azure-stack-edge-gpu-configure-gpu-modules.md) på enheten.

- Den andra metoden kräver att du aktiverar multi-process-tjänsten på dina nVidia-GPU: er. MPS är en körnings tjänst som låter flera processer som använder CUDA köras samtidigt på en enda delad GPU. Med MPS kan du överlappa kernel-och memcopy-åtgärder från olika processer på GPU: n för att uppnå maximal användning. Mer information finns i [multi-process service](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Ta hänsyn till följande varningar när du använder den här metoden:
    
    - Med hjälp av MPS kan du ange fler flaggor i GPU-distributionen.
    - Du kan ange decimal användning via MPS och därmed begränsa användningen av varje program som distribueras på enheten. Du kan ange vilken GPU-procent som ska användas för varje app under `env` avsnittet i `deployment.yaml` genom att lägga till följande parameter: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU-användning
 
När du delar GPU: er på arbets belastningar som distribuerats på enheten kan du använda NVIDIA-SMI (System Management Interface). NVIDIA-SMI är ett kommando rads verktyg som hjälper dig att hantera och övervaka nVidia GPU-enheter. Mer information finns i [NVIDIA System Management Interface](https://developer.nvidia.com/nvidia-system-management-interface).

Om du vill visa GPU-användning ansluter du först till PowerShell-gränssnittet på enheten. Kör `Get-HcsNvidiaSmi` kommandot och Visa NVIDIA SMI-utdata. Du kan också se hur GPU-användningen förändras genom att aktivera MPS och sedan distribuera flera arbets belastningar på enheten. Mer information finns i [Aktivera multi-process service](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Nästa steg

- [GPU-delning för Kubernetes-distributioner på din Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [GPU-delning för IoT-distributioner på din Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
