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
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630290"
---
**Compute-mål kan återanvändas från ett utbildnings jobb till nästa.** När du till exempel har anslutit en virtuell dator till din arbets yta kan du återanvända den för flera jobb. För maskin inlärnings pipeliner använder du lämpligt [pipeline-steg](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) för varje beräknings mål.

Du kan använda någon av följande resurser för ett utbildnings mål för de flesta jobb. Alla resurser kan inte användas för automatisk maskin inlärning, maskin inlärnings pipeliner eller designer.

|Utbildnings &nbsp; mål|[Automatiserad maskininlärning](../articles/machine-learning/concept-automated-ml.md) | [Pipelines för maskininlärning](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokal dator](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Yes | &nbsp; | &nbsp; |
|[Azure Machine Learning beräknings kluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ja | Ja | Ja |
|[Azure Machine Learning-beräkningsinstans](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ja (via SDK)  | Yes |  |
|[VIRTUELL fjärrdator](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ja  | Ja | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ja (endast SDK lokalt läge) | Yes | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Yes | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Yes | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Yes | &nbsp; |

> [!TIP]
> Beräknings instansen har 120 GB OS-disk. Om du får slut på disk utrymme kan du [använda terminalen](../articles/machine-learning/how-to-access-terminal.md) för att rensa minst 1-2 GB innan du [stoppar eller startar om](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage) beräknings instansen.
