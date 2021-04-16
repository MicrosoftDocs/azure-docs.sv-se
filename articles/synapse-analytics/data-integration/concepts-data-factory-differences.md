---
title: Skillnader från Azure Data Factory
description: Lär dig hur dataintegreringsfunktionerna i Azure Synapse Analytics skiljer sig från Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 144bdf5e94f753090dd73e5839b6c1fd25f11811
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567646"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Dataintegrering i Azure Synapse Analytics kontra Azure Data Factory

I Azure Synapse Analytics baseras dataintegreringsfunktioner som Synapse-pipelines och dataflöden på Azure Data Factory. Mer information finns i [vad som Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Tillgängliga funktioner i ADF-& Azure Synapse Analytics

Kontrollera tabellen nedan för tillgänglighet av funktioner:

| Kategori                 | Funktion    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Använda SSIS och SSIS Integration Runtime | ✓ | ✗ |
|                          | Stöd för Integration Runtime (dataflöden) | ✓ | ✗ |
|                          | Integration Runtime delning | ✓<br><small>*Kan delas mellan olika datafabriker* | ✗ |
|                          | Time to Live | ✓ | ✗ |
| **Pipelines-aktiviteter** | SSIS-paketaktivitet | ✓ | ✗ |
|                          | Stöd för Power Query aktivitet | ✓ | ✓ |
| **Mallgalleri och Kunskapscenter** | Lösningsmallar | ✓<br><small>*Azure Data Factory mallgalleriet* | ✓<br><small>*Kunskapscenter för Synapse-arbetsyta* |
| **Git-lagringsplatsintegrering** | GIT-integrering | ✓ | ✓ |
| **Övervakning**           | Övervakning av Spark-jobb för Dataflöde | ✗ | ✓<br><small>*Utnyttja Synapse Spark-poolerna* |
|                          | Integrering med Azure Monitor | ✓ | ✗ |
| **Ursprung** | Stöder publicering av pipeline-härledningsdata till Purview  | ✓ | ✗ |  

> [!Note]
> **Time to Live** är Azure Integration Runtime inställning som gör att Spark-klustret  kan hålla sig varmt under en viss tid efter en körning av dataflödet.
>


## <a name="next-steps"></a>Nästa steg

Kom igång med dataintegrering i Synapse-arbetsytan genom att lära dig hur du matar in data till [ett Azure Data Lake Storage gen2-konto.](data-integration-data-lake.md)
