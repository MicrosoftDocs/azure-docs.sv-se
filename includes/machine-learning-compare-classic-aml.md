---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563203"
---
I följande tabell sammanfattas viktiga skillnader mellan ML Studio (klassisk) och Azure Machine Learning.

| Funktion | ML Studio (klassisk) | Azure Machine Learning |
|---| --- | --- |
| Dra och släpp gränssnitt | Klassisk miljö | Uppdaterad erfarenhet – [Azure Machine Learning designer](../articles/machine-learning/concept-designer.md)| 
| Kod-SDK: er | Stöd saknas | Fullständigt integrerat med [Azure Machine Learning python](/python/api/overview/azure/ml/) -och [R](https://github.com/Azure/azureml-sdk-for-r) SDK: er |
| Experiment | Skalbar (10 GB inlärnings data gräns) | Skala med beräknings mål |
| Inlärnings mål | Patentskyddat beräknings mål, endast CPU-stöd | Brett utbud av anpassningsbara [inlärnings mål](../articles/machine-learning/concept-compute-target.md#train). Innehåller stöd för GPU och CPU | 
| Beräknings mål för distribution | Eget webb tjänst format, inte anpassningsbart | Brett utbud av anpassningsbara [beräknings mål för distribution](../articles/machine-learning/concept-compute-target.md#deploy). Innehåller stöd för GPU och CPU |
| ML pipeline | Stöds inte | Bygg flexibla, modulära [pipelines](../articles/machine-learning/concept-ml-pipelines.md) för att automatisera arbets flöden |
| MLOps | Grundläggande modell hantering och distribution; Distributioner endast CPU | Enhets version (modell, data, arbets flöden), automatisering av arbets flöde, integrering med CICD-verktyg, processor-och GPU-distributioner med [mera](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Modell format | Eget format, endast Studio (klassiskt) | Flera format som stöds beroende på utbildnings jobb typ |
| Automatisk modell inlärning och inställning för att justera dem |  Stöds inte | [Stöds](../articles/machine-learning/concept-automated-ml.md). Kod-första och inga kod alternativ. | 
| Data avkänning | Stöds inte | [Stöds](../articles/machine-learning/how-to-monitor-datasets.md) |
| Data märknings projekt | Stöds inte | [Stöds](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Rollbaserad åtkomstkontroll (RBAC) | Endast deltagare och ägar roll | [Flexibel roll definition och RBAC-kontroll](../articles/machine-learning/how-to-assign-roles.md) |
| AI-Galleri | Stöds ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Stöd saknas <br><br> Lär dig med [exempel på python SDK-anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks). |