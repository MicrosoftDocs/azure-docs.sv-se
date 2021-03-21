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
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183649"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Data integrering i Azure Synapse Analytics kontra Azure Data Factory

I Azure Synapse Analytics baseras data integrerings funktionerna som Synapse-pipeliner och data flöden på Azure Data Factory. Mer information finns i [Vad är Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Tillgängliga funktioner i ADF & Azure Synapse Analytics

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
| **Ursprung** | Stöder publicering av pipeline härkomst-data till avdelningens kontroll  | ✓ | ✗ |  

> [!Note]
> **Time to Live** är en Azure integration runtime-inställning som gör det möjligt för Spark-klustret att *hålla sig varm* under en viss tids period efter att data flödet körts.
>


## <a name="next-steps"></a>Nästa steg

Kom igång med data integrering på din Synapse-arbetsyta genom att lära dig att mata [in data i ett Azure Data Lake Storage Gen2-konto](data-integration-data-lake.md).
