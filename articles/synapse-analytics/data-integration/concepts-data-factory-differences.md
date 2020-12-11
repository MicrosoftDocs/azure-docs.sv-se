---
title: Skillnader från Azure Data Factory
description: Lär dig hur data integrerings funktionerna i Azure Synapse Analytics skiljer sig från Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109155"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Data integrering i Azure Synapse Analytics kontra Azure Data Factory

I Azure Synapse Analytics baseras data integrerings funktionerna som Synapse-pipeliner och data flöden på Azure Data Factory. Mer information finns i [Vad är Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Tillgängliga funktioner i Azure Data Factory och Azure Synapse Analytics

Sök efter tillgänglighet för funktioner i tabellen nedan:

| Kategori                 | Funktion    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Använda SSIS och SSIS Integration Runtime | ✓ | ✗ |
|                          | Stöd för Integration Runtime över flera regioner (data flöden) | ✓ | ✗ |
|                          | Integration Runtime delning | ✓<br><small>*Kan delas mellan olika data fabriker* | ✗ |
|                          | Time to Live | ✓ | ✗ |
| **Pipeline-aktiviteter** | SSIS-paket-aktivitet | ✓ | ✗ |
|                          | Stöd för Power Query-aktivitet | ✓ | ✓ |
| **Mallgalleriet och kunskaps Center** | Lösningsmallar | ✓<br><small>*Galleri för Azure Data Factory mallar* | ✓<br><small>*Synapse-arbetsytans kunskaps Center* |
| **Integrering av GIT-databasen** | GIT-integrering | ✓ | ✓ |
| **Övervakning**           | Övervakning av Spark-jobb för data flöde | ✗ | ✓<br><small>*Utnyttja Synapse Spark-pooler* |
|                          | Integrering med Azure Monitor | ✓ | ✗ |

> [!Note]
> **Time to Live** är en Azure integration runtime-inställning som gör det möjligt för Spark-klustret att *hålla sig varm* under en viss tids period efter att data flödet körts.
>


## <a name="next-steps"></a>Nästa steg

Kom igång med data integrering på din Synapse-arbetsyta genom att lära dig att mata [in data i ett Azure Data Lake Storage Gen2-konto](data-integration-data-lake.md).
