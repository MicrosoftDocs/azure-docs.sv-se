---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505128"
---
Det beräknings mål som du använder för att vara värd för din modell påverkar kostnaden och tillgängligheten för den distribuerade slut punkten. Använd den här tabellen för att välja ett lämpligt beräknings mål.

| Beräkningsmål | Används för | GPU-stöd | FPGA-stöd | Beskrivning |
| ----- | ----- | ----- | ----- | ----- |
| [Lokal &nbsp; webb &nbsp; tjänst](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testning/fel sökning | &nbsp; | &nbsp; | Används för begränsad testning och fel sökning. Maskin varu accelerationen beror på användningen av bibliotek i det lokala systemet.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Real tids härledning |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webb tjänst distribution) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Använd för storskaliga produktions distributioner. Ger snabb svars tid och automatisk skalning av den distribuerade tjänsten. Automatisk skalning i kluster stöds inte via Azure Machine Learning SDK. Om du vill ändra noderna i AKS-klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Portal. <br/><br/> Stöds i designern. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testning eller utveckling | &nbsp;  | &nbsp; | Använd för processorbaserade CPU-baserade arbets belastningar som kräver mindre än 48 GB RAM-minne. <br/><br/> Stöds i designern. |
| [Azure Machine Learning-beräkningskluster](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Batch- &nbsp; härledning | [Ja](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (Machine Learning-pipeline) | &nbsp;  | Kör batch-poängsättning vid Server lös beräkning. Stöder normal och låg prioritet för virtuella datorer. Inget stöd för real tids härledning.|

> [!NOTE]
> Även om Compute-mål som Local, Azure Machine Learning Compute och Azure Machine Learning beräknings kluster stöder GPU för utbildning och experimentering, kan du använda GPU för _en härledning när den distribueras som en webb tjänst_ bara stöds på AKS.
>
> Användning av en GPU för härledning _när poäng med en Machine Learning-pipeline_ stöds endast på Azure Machine Learning Compute.
> 
> När du väljer ett kluster-SKU, skala först upp och sedan ut. Börja med en dator som har 150% av RAM-minnet som din modell kräver, profilera resultatet och hitta en dator som har den prestanda du behöver. När du har lärt dig kan du öka antalet datorer så att de passar dina behov av samtidiga störningar.

> [!NOTE]
> * Container instances lämpar sig bara för små modeller som är mindre än 1 GB stora.
> * Använd AKS-kluster med en nod för utveckling och testning av större modeller.