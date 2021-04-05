---
title: Konfigurera arbets belastnings prioritet för dedikerad SQL-pool
description: Lär dig hur du ställer in viktig information i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681293"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Konfigurera arbets belastnings prioritet i dedikerad SQL-pool för Azure Synapse Analytics

Genom att ange prioritet i dedikerad SQL-pool för Azure Synapse kan du påverka schemaläggningen av frågor. Frågor med högre prioritet schemaläggs att köras innan frågor med lägre prioritet. Om du vill tilldela prioritet för frågor måste du skapa en klassificering för arbets belastning.

## <a name="create-a-workload-classifier-with-importance"></a>Skapa en klassificering av arbets belastningar med prioritet

Ofta i ett informations lager scenario har du användare i ett upptaget system som behöver köra sina frågor snabbt.  Användaren kan vara chefer för företaget som behöver köra rapporter eller så kan användaren vara en analytiker som kör en adhoc-fråga. För att tilldela prioritet, skapar du en klassificering av arbets belastningar och prioriteten tilldelas en fråga.  I exemplen nedan används  [klassificerings-syntaxen skapa arbets belastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att skapa två klassificerare. `Membername` kan vara en enskild användare eller en grupp.  Om du vill hitta befintliga dedikerade användare av SQL-pooler kör du:

```sql
Select name from sys.sysusers
```

För att skapa en arbets belastnings klassificering för en användare med en högre prioritets körning:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Så här skapar du en arbets belastnings klassificering för en användare som kör adhoc-frågor med lägre prioritets körning:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Nästa steg

- Mer information om arbets belastnings hantering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md)
- Mer information om prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till hantera och övervaka arbets belastnings prioritet](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
