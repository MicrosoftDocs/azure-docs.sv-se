---
title: Vad är beräknings mål
titleSuffix: Azure Machine Learning
description: Lär dig hur du anger en beräknings resurs eller miljö för att träna eller distribuera din modell med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 16c3ac10af7d39ec35cde1cd9d279bced54fd8aa
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062513"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Vad är beräkningsmål i Azure Machine Learning?

Ett *beräknings mål* är en viss beräknings resurs eller miljö där du kör ditt utbildnings skript eller är värd för tjänst distributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräknings resurs. Med hjälp av beräknings mål kan du enkelt ändra beräknings miljön utan att behöva ändra koden.

I en typisk modell utvecklings livs cykel kan du:

1. Börja med att utveckla och experimentera med en liten mängd data. I det här skedet använder du din lokala miljö, till exempel en lokal dator eller en molnbaserad virtuell dator (VM), som beräknings mål.
1. Skala upp till större data eller distribuera utbildning genom att använda någon av dessa [inlärnings mål](#train).
1. När modellen är klar distribuerar du den till en webb värd miljö eller IoT-enhet med någon av dessa [distributions mål](#deploy).

De beräknings resurser som du använder för beräknings målen är kopplade till en [arbets yta](concept-workspace.md). Andra beräknings resurser än den lokala datorn delas av användare av arbets ytan.

## <a name="training-compute-targets"></a><a name="train"></a> Inlärnings mål

Azure Machine Learning har varierande stöd för olika beräknings mål. En typisk modell utvecklings livs cykel börjar med utveckling eller experimentering på en liten mängd data. I det här skedet använder du en lokal miljö som din lokala dator eller en molnbaserad virtuell dator. När du skalar din utbildning på större data uppsättningar eller utför distribuerad utbildning, använder du Azure Machine Learning Compute för att skapa ett kluster med en eller flera noder som autoskalar varje gång som du skickar in en körning. Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Lär dig mer om hur du [skickar in en utbildning som körs till ett beräknings mål](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Beräknings mål för härledning

Följande beräknings resurser kan användas som värd för modell distributionen.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

När du utför en härledning skapar Azure Machine Learning en Docker-behållare som är värd för modellen och tillhör ande resurser som krävs för att använda den. Den här behållaren används sedan i något av följande distributions scenarier:

* Som en *webb tjänst* som används för real tids härledning. Webb tjänst distributioner använder något av följande beräknings mål:

    * [Lokal dator](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning-beräkningsinstans](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (för hands version). Distribution till Functions bygger bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av functions. Mer information finns i [distribuera en maskin inlärnings modell till Azure Functions (för hands version)](how-to-deploy-functions.md).

* Som en slut punkt för _batch-härledning_ som används för att regelbundet bearbeta batchar med data. Batch-inferences använder [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md).

* Till en _IoT-enhet_ (för hands version). Distribution till en IoT-enhet förlitar sig bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av Azure IoT Edge. Mer information finns i [distribuera som en IoT Edge modul (för hands version)](../iot-edge/tutorial-deploy-machine-learning.md).

Lär dig [hur och hur du distribuerar din modell till ett beräknings mål](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (hanterad)

En hanterad beräknings resurs skapas och hanteras av Azure Machine Learning. Den här beräkningen är optimerad för Machine Learning-arbetsbelastningar. Azure Machine Learning beräknings kluster och [beräknings instanser](concept-compute-instance.md) är de enda hanterade beräkningarna.

Du kan skapa Azure Machine Learning beräknings instanser eller beräknings kluster från:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).
* Python SDK och CLI:
    * [Beräknings instans](how-to-create-manage-compute-instance.md).
    * [Beräknings kluster](how-to-create-attach-compute-cluster.md).
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (för hands version).
* En Azure Resource Manager-mall. En exempel-mall finns i [skapa ett Azure Machine Learning beräknings kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Ett Machine Learning- [tillägg för Azure CLI](reference-azure-machine-learning-cli.md#resource-management).

När du skapar den här typen av beräknings resurser ingår dessa automatiskt i din arbets yta, till skillnad från andra typer av beräknings mål.


|Funktion  |Beräkningskluster  |Beräkninsinstans  |
|---------|---------|---------|
|Kluster med en eller flera noder     |    **&check;**       |         |
|Autoskalar varje gången du skickar en körning     |     **&check;**      |         |
|Automatisk kluster hantering och schemaläggning av jobb     |   **&check;**        |     **&check;**      |
|Stöd för både CPU-och GPU-resurser     |  **&check;**         |    **&check;**       |


> [!NOTE]
> När ett beräknings *kluster* är inaktivt skalas det till 0 noder, så du betalar inte när det inte används. En beräknings *instans* är alltid aktive rad och kan inte autoskalas. Du bör [stoppa beräknings instansen](how-to-create-manage-compute-instance.md#manage) när du inte använder den för att undvika extra kostnader.

### <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

När du väljer en Node-storlek för en hanterad beräknings resurs i Azure Machine Learning kan du välja mellan Välj VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för Linux och Windows för olika arbets belastningar. Läs mer i VM- [typer och storlekar](../virtual-machines/sizes.md).

Det finns några undantag och begränsningar för att välja en VM-storlek:

* En del VM-serien stöds inte i Azure Machine Learning.
* En del VM-serien är begränsad. Om du vill använda en begränsad serie kontaktar du supporten och begär en kvot ökning för serien. Information om hur du kontaktar support finns i [Support alternativ för Azure](https://azure.microsoft.com/support/options/).

I följande tabell finns mer information om vilka serier och begränsningar som stöds.

| **VM-serien som stöds**  | **Begränsningar** | **Kategori** | **Stöds av** |
|------------|------------|------------|------------|
| D | Inga. | Generellt syfte | Beräknings kluster och instans |
| DDSv4 | Inga. | Generellt syfte | Beräknings kluster och instans |
| Dv2 | Inga. | Generellt syfte | Beräknings kluster och instans |
| Dv3 | Inga.| Generellt syfte | Beräknings kluster och instans |
| DSv2 | Inga. | Generellt syfte | Beräknings kluster och instans |
| DSv3 | Inga.| Generellt syfte | Beräknings kluster och instans |
| EAv4 | Inga. | Minnesoptimerad | Beräknings kluster och instans |
| Ev3 | Inga. | Minnesoptimerad | Beräknings kluster och instans |
| FSv2 | Inga. | Beräkningsoptimerad | Beräknings kluster och instans |
| H | Inga. | Databehandling med höga prestanda | Beräknings kluster och instans |
| HB | Kräver godkännande. | Databehandling med höga prestanda | Beräknings kluster och instans |
| HBv2 | Kräver godkännande. |  Databehandling med höga prestanda | Beräknings kluster och instans |
| HCS UPPDATERINGSKLIENTEN | Kräver godkännande. |  Databehandling med höga prestanda | Beräknings kluster och instans |
| M | Kräver godkännande. | Minnesoptimerad | Beräknings kluster och instans |
| NC | Inga. |  GPU | Beräknings kluster och instans |
| NC-kampanj | Inga. | GPU | Beräknings kluster och instans |
| NCsv2 | Kräver godkännande. | GPU | Beräknings kluster och instans |
| NCsv3 | Kräver godkännande. | GPU | Beräknings kluster och instans |  
| NDs | Kräver godkännande. | GPU | Beräknings kluster och instans | 
| NDv2 | Kräver godkännande. | GPU | Beräknings kluster och instans | 
| NV | Inga. | GPU | Beräknings kluster och instans | 
| NVv3 | Kräver godkännande. | GPU | Beräknings kluster och instans | 


Även om Azure Machine Learning stöder dessa VM-serier är de kanske inte tillgängliga i alla Azure-regioner. Information om hur du kontrollerar om VM-serien är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning stöder inte alla VM-storlekar som stöds av Azure Compute. Använd någon av följande metoder för att visa en lista över tillgängliga VM-storlekar:
> * [REST-API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Beräknings isolering

Azure Machine Learning Compute erbjuder VM-storlekar som är isolerade till en viss maskin varu typ och är dedikerad till en enda kund. Isolerade VM-storlekar lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunders arbets belastningar av orsaker som innehåller krav på efterlevnad och regler. Användning av en isolerad storlek garanterar att den virtuella datorn är den enda som körs på den aktuella Server instansen.

De aktuella isolerade VM-erbjudandena inkluderar:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA-stöd

Mer information om isolering finns [i isolering i det offentliga Azure-molnet](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Ohanterad beräkning

Ett ohanterat beräknings mål hanteras *inte* av Azure Machine Learning. Du skapar den här typen av beräknings mål utanför Azure Machine Learning och kopplar den sedan till din arbets yta. Ohanterade beräknings resurser kan kräva ytterligare åtgärder för att underhålla eller förbättra prestanda för Machine Learning-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Använd ett beräknings mål för att träna din modell](how-to-set-up-training-targets.md)
* [Distribuera din modell till ett beräknings mål](how-to-deploy-and-where.md)
