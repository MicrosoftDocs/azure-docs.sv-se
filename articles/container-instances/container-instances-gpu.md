---
title: Distribuera GPU-aktiverad containerinstans
description: Lär dig hur du distribuerar Azure-containerinstanser för att köra beräkningsintensiva containerappar med GPU-resurser.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 6ffe4840d024c1e1f551966d05673c4ba83e1259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764071"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Distribuera containerinstanser som använder GPU-resurser

Om du vill köra vissa beräkningsintensiva arbetsbelastningar på Azure Container Instances distribuerar du [dina containergrupper](container-instances-container-groups.md) med *GPU-resurser*. Containerinstanserna i gruppen kan komma åt en eller flera NVIDIA Tesla-GPU:er när du kör containerarbetsbelastningar som CUDA och djupinlärningsprogram.

Den här artikeln visar hur du lägger till GPU-resurser när du distribuerar en containergrupp med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md) [Resource Manager mall](container-instances-multi-container-group.md). Du kan också ange GPU-resurser när du distribuerar en containerinstans med hjälp av Azure Portal.

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

I förhandsversionen gäller följande begränsningar när du använder GPU-resurser i containergrupper. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Stöd kommer att läggas till för ytterligare regioner över tid.

**Os-typer som** stöds: endast Linux

**Ytterligare begränsningar:** GPU-resurser kan inte användas när du distribuerar en containergrupp till ett [virtuellt nätverk](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Om GPU-resurser

### <a name="count-and-sku"></a>Antal och SKU

Om du vill använda GPU:er i en containerinstans anger du *en GPU-resurs* med följande information:

* **Antal** – Antalet GPU:er: **1**, **2** eller **4**.
* **SKU** – GPU-SKU:n: **K80,** **P100** eller **V100**. Varje SKU mappar till NVIDIA Tesla GPU i en av följande Azure GPU-aktiverade VM-familjer:

  | SKU | VM-familj |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

När du distribuerar GPU-resurser anger du lämpliga PROCESSOR- och minnesresurser för arbetsbelastningen, upp till de högsta värden som visas i föregående tabell. Dessa värden är för närvarande större än processor- och minnesresurserna som är tillgängliga i containergrupper utan GPU-resurser.  

> [!IMPORTANT]
> [Standardprenumerationens](container-instances-quotas.md) gränser (kvoter) för GPU-resurser skiljer sig åt efter SKU. Standardgränserna för CPU för SKU:erna P100 och V100 är inledningsvis inställda på 0. Om du vill begära en ökning i en tillgänglig region skickar du en [Azure-supportbegäran.][azure-support]

### <a name="things-to-know"></a>Saker att känna till

* **Distributionstid** – Det tar upp till **8–10** minuter att skapa en containergrupp som innehåller GPU-resurser. Detta beror på den extra tid det tar att etablera och konfigurera en virtuell GPU-dator i Azure. 

* **Prissättning** – I likhet med containergrupper utan GPU-resurser debiterar Azure för resurser som förbrukas under en containergrupps varaktighet med GPU-resurser.  Varaktigheten beräknas från tiden för att hämta den första containerns avbildning tills containergruppen avslutas. Det omfattar inte den tid det tar att distribuera containergruppen.

  Se [prisinformation.](https://azure.microsoft.com/pricing/details/container-instances/)

* **CUDA-drivrutiner** – Containerinstanser med GPU-resurser före etablering med NVIDIA CUDA-drivrutiner och containerkörningar, så du kan använda containeravbildningar som har utvecklats för CUDA-arbetsbelastningar.

  Vi stöder endast CUDA 9.0 i det här skedet. Du kan till exempel använda följande basavbildningar för Docker-filen:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML-exempel

Ett sätt att lägga till GPU-resurser är att distribuera en containergrupp med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md). Kopiera följande YAML till en ny fil med namnet *gpu-deploy-aci.yaml* och spara sedan filen. Denna YAML skapar en containergrupp med namnet *gpucontainergroup som* anger en containerinstans med en K80 GPU. Instansen kör ett exempelprogram för CUDA-vektor addition. Resursbegäranden är tillräckliga för att köra arbetsbelastningen.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Distribuera containergruppen med kommandot [az container create][az-container-create] och ange YAML-filnamnet för `--file` parametern. Du måste ange namnet på en resursgrupp och en plats för containergruppen, till exempel *eastus som* stöder GPU-resurser.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Distributionen tar normalt flera minuter för att slutföras. Sedan startar containern och kör en tilläggsåtgärd för CUDA-vektorn. Kör kommandot [az container logs][az-container-logs] för att visa loggutdata:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Utdata:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager mallexempel

Ett annat sätt att distribuera en containergrupp med GPU-resurser är att använda [Resource Manager mall](container-instances-multi-container-group.md). Börja med att skapa en fil `gpudeploy.json` med namnet och kopiera sedan följande JSON till den. Det här exemplet distribuerar en containerinstans med en V100 GPU som kör ett [TensorFlow-träningsjobb](https://www.tensorflow.org/) mot MNIST-datauppsättning. Resursbegäranden är tillräckliga för att köra arbetsbelastningen.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Distribuera mallen med kommandot [az deployment group][az-deployment-group-create] create. Du måste ange namnet på en resursgrupp som har skapats i en region, till exempel *eastus som* stöder GPU-resurser.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Distributionen tar normalt flera minuter för att slutföras. Sedan startar containern och kör TensorFlow-jobbet. Kör kommandot [az container logs][az-container-logs] för att visa loggutdata:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Utdata:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom det kan vara dyrt att använda GPU-resurser bör du se till att containrarna inte körs oväntat under långa perioder. Övervaka dina containrar i Azure Portal eller kontrollera statusen för en containergrupp med [kommandot az container show.][az-container-show] Exempel:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

När du är klar med de containerinstanser som du skapade tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar en containergrupp med hjälp av en [YAML-fil](container-instances-multi-container-yaml.md) [Resource Manager mall](container-instances-multi-container-group.md).
* Läs mer om [GPU-optimerade VM-storlekar](../virtual-machines/sizes-gpu.md) i Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
