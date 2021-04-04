---
title: Distribuera GPU-aktiverad container instans
description: Lär dig hur du distribuerar Azure Container instances för att köra beräknings intensiva behållar appar med GPU-resurser.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 0d645d1fce24d1324e485d74e20bcf492d4444a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93127016"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Distribuera behållar instanser som använder GPU-resurser

Om du vill köra vissa beräknings intensiva arbets belastningar på Azure Container Instances distribuerar du [behållar grupper](container-instances-container-groups.md) med *GPU-resurser*. Behållar instanserna i gruppen kan komma åt en eller flera NVIDIA Tesla-GPU: er när du kör behållar arbets belastningar som CUDA och djup inlärnings program.

Den här artikeln visar hur du lägger till GPU-resurser när du distribuerar en behållar grupp med hjälp av en [yaml-fil](container-instances-multi-container-yaml.md) eller [Resource Manager-mall](container-instances-multi-container-group.md). Du kan också ange GPU-resurser när du distribuerar en behållar instans med hjälp av Azure Portal.

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

I för hands versionen gäller följande begränsningar när du använder GPU-resurser i behållar grupper. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Support kommer att läggas till för ytterligare regioner över tid.

**OS-typer som stöds**: endast Linux

**Ytterligare begränsningar**: GPU-resurser kan inte användas när du distribuerar en behållar grupp till ett [virtuellt nätverk](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Om GPU-resurser

### <a name="count-and-sku"></a>Antal och SKU

Om du vill använda GPU: er i en behållar instans anger du en *GPU-resurs* med följande information:

* **Count** – antalet GPU: **1**, **2** eller **4**.
* **SKU** – GPU SKU: **K80**, **P100** eller **V100**. Varje SKU mappar till NVIDIA Tesla GPU i en av följande Azure GPU-aktiverade VM-familjer:

  | SKU | VM-serien |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

När du distribuerar GPU-resurser ställer du in processor-och minnes resurser som är lämpliga för arbets belastningen, upp till de maximala värdena som visas i tabellen ovan. Dessa värden är för närvarande större än de processor-och minnes resurser som är tillgängliga i behållar grupper utan GPU-resurser.  

> [!IMPORTANT]
> Standard [begränsningar för prenumerationer](container-instances-quotas.md) (kvoter) för GPU-resurser skiljer sig från SKU. Standard processor gränserna för P100-och V100-SKU: er ställs först in på 0. Om du vill begära en ökning av en tillgänglig region kan du skicka en [support förfrågan för Azure][azure-support].

### <a name="things-to-know"></a>Saker att känna till

* **Distributions tiden** för att skapa en behållar grupp som innehåller GPU-resurser tar upp till **8-10 minuter**. Detta beror på ytterligare tid för att etablera och konfigurera en virtuell GPU-dator i Azure. 

* **Priser** – liknande behållar grupper utan GPU-resurser, fakturerar Azure för resurser *som* förbrukas under en behållar grupp med GPU-resurser. Varaktigheten beräknas från tiden för att hämta din första behållares avbildning tills behållar gruppen avslutas. Den omfattar inte tiden för att distribuera behållar gruppen.

  Se [pris information](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA-drivrutiner** – behållar instanser med GPU-resurser är företablerade med NVIDIA CUDA-drivrutiner och behållar körningar, så du kan använda behållar avbildningar som har utvecklats för CUDA-arbetsbelastningar.

  Vi stöder endast CUDA 9,0 i det här skedet. Du kan till exempel använda följande bas avbildningar för din Docker-fil:
  * [NVIDIA/CUDA: 9.0-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML-exempel

Ett sätt att lägga till GPU-resurser är att distribuera en behållar grupp med hjälp av en [yaml-fil](container-instances-multi-container-yaml.md). Kopiera följande YAML till en ny fil med namnet *GPU-Deploy-ACI. yaml* och spara sedan filen. Den här YAML skapar en behållar grupp med namnet *gpucontainergroup* som anger en behållar instans med en K80-GPU. Instansen kör ett exempel program för CUDA Vector addition. Resurs begär Anden räcker för att köra arbets belastningen.

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

Distribuera behållar gruppen med kommandot [AZ container Create][az-container-create] , och ange parameterns yaml-filnamn `--file` . Du måste ange namnet på en resurs grupp och en plats för behållar gruppen, till exempel *öster* som stöder GPU-resurser.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Distributionen tar normalt flera minuter för att slutföras. Sedan startar behållaren och kör en CUDA Vector addition-åtgärd. Kör kommandot [AZ container logs][az-container-logs] för att Visa loggens utdata:

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

## <a name="resource-manager-template-example"></a>Exempel på en Resource Manager-mall

Ett annat sätt att distribuera en behållar grupp med GPU-resurser är att använda en [Resource Manager-mall](container-instances-multi-container-group.md). Börja med att skapa en fil med namnet `gpudeploy.json` och kopiera sedan följande JSON till den. I det här exemplet distribueras en behållar instans med en V100-GPU som kör ett [TensorFlow](https://www.tensorflow.org/) -tränings jobb mot MNIST-datauppsättningen. Resurs begär Anden räcker för att köra arbets belastningen.

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

Distribuera mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] . Du måste ange namnet på en resurs grupp som har skapats i en region, till exempel *öster* som stöder GPU-resurser.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Distributionen tar normalt flera minuter för att slutföras. Sedan startar behållaren och kör TensorFlow-jobbet. Kör kommandot [AZ container logs][az-container-logs] för att Visa loggens utdata:

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

Eftersom det kan vara kostsamt att använda GPU-resurser, se till att dina behållare inte körs utan förvarning under långa perioder. Övervaka dina behållare i Azure Portal eller kontrol lera statusen för en behållar grupp med kommandot [AZ container show][az-container-show] . Exempel:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

När du är klar med de behållar instanser som du har skapat tar du bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar en behållar grupp med en [yaml-fil](container-instances-multi-container-yaml.md) eller en [Resource Manager-mall](container-instances-multi-container-group.md).
* Lär dig mer om [GPU-optimerade VM-storlekar](../virtual-machines/sizes-gpu.md) i Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
